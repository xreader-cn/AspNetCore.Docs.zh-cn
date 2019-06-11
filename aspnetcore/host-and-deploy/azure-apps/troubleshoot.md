---
title: 对 Azure 应用服务上的 ASP.NET Core 进行故障排除
author: guardrex
description: 了解如何诊断 ASP.NET Core Azure 应用服务部署问题。
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2019
uid: host-and-deploy/azure-apps/troubleshoot
ms.openlocfilehash: 7a0bb7df27ebbea0eac79771452295846fad563a
ms.sourcegitcommit: a04eb20e81243930ec829a9db5dd5de49f669450
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2019
ms.locfileid: "66470441"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service"></a><span data-ttu-id="2d040-103">对 Azure 应用服务上的 ASP.NET Core 进行故障排除</span><span class="sxs-lookup"><span data-stu-id="2d040-103">Troubleshoot ASP.NET Core on Azure App Service</span></span>

<span data-ttu-id="2d040-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2d040-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="2d040-105">本文说明了如何使用 Azure 应用服务的诊断工具诊断 ASP.NET Core 应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="2d040-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue using Azure App Service's diagnostic tools.</span></span> <span data-ttu-id="2d040-106">有关其他故障排除建议，请参阅 Azure 文档中的 [Azure 应用服务诊断概述](/azure/app-service/app-service-diagnostics)和[如何：在 Azure 应用服务中监视应用](/azure/app-service/web-sites-monitor)。</span><span class="sxs-lookup"><span data-stu-id="2d040-106">For additional troubleshooting advice, see [Azure App Service diagnostics overview](/azure/app-service/app-service-diagnostics) and [How to: Monitor Apps in Azure App Service](/azure/app-service/web-sites-monitor) in the Azure documentation.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="2d040-107">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="2d040-107">App startup errors</span></span>

<span data-ttu-id="2d040-108">**502.5 进程故障**工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="2d040-108">**502.5 Process Failure** The worker process fails.</span></span> <span data-ttu-id="2d040-109">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="2d040-109">The app doesn't start.</span></span>

<span data-ttu-id="2d040-110">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试启动工作进程，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="2d040-110">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="2d040-111">检查应用程序事件日志通常可帮助解决此类型的问题。</span><span class="sxs-lookup"><span data-stu-id="2d040-111">Examining the Application Event Log often helps troubleshoot this type of problem.</span></span> <span data-ttu-id="2d040-112">[应用程序事件日志](#application-event-log)部分中介绍了访问日志。</span><span class="sxs-lookup"><span data-stu-id="2d040-112">Accessing the log is explained in the [Application Event Log](#application-event-log) section.</span></span>

<span data-ttu-id="2d040-113">配置错误的应用导致工作进程失败时，将返回“502.5 进程故障”  错误页面：</span><span class="sxs-lookup"><span data-stu-id="2d040-113">The *502.5 Process Failure* error page is returned when a misconfigured app causes the worker process to fail:</span></span>

![显示“502.5 进程故障”页面的浏览器窗口](troubleshoot/_static/process-failure-page.png)

<span data-ttu-id="2d040-115">**500 内部服务器错误**</span><span class="sxs-lookup"><span data-stu-id="2d040-115">**500 Internal Server Error**</span></span>

<span data-ttu-id="2d040-116">应用启动，但某个错误阻止了服务器完成请求。</span><span class="sxs-lookup"><span data-stu-id="2d040-116">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="2d040-117">在启动期间或在创建响应时，应用的代码内出现此错误。</span><span class="sxs-lookup"><span data-stu-id="2d040-117">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="2d040-118">响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”  。</span><span class="sxs-lookup"><span data-stu-id="2d040-118">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="2d040-119">应用程序事件日志通常表明应用正常启动。</span><span class="sxs-lookup"><span data-stu-id="2d040-119">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="2d040-120">从服务器的角度来看，这是正确的。</span><span class="sxs-lookup"><span data-stu-id="2d040-120">From the server's perspective, that's correct.</span></span> <span data-ttu-id="2d040-121">应用已启动，但无法生成有效的响应。</span><span class="sxs-lookup"><span data-stu-id="2d040-121">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="2d040-122">[在 Kudu 控制台中运行应用](#run-the-app-in-the-kudu-console)或[启用 ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)以解决该问题。</span><span class="sxs-lookup"><span data-stu-id="2d040-122">[Run the app in the Kudu console](#run-the-app-in-the-kudu-console) or [enable the ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) to troubleshoot the problem.</span></span>

::: moniker range="= aspnetcore-2.2"

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="2d040-123">500.30 进程内启动失败</span><span class="sxs-lookup"><span data-stu-id="2d040-123">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="2d040-124">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="2d040-124">The worker process fails.</span></span> <span data-ttu-id="2d040-125">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="2d040-125">The app doesn't start.</span></span>

<span data-ttu-id="2d040-126">ASP.NET Core 模块尝试进程内启动 .NET Core CLR，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="2d040-126">The ASP.NET Core Module attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="2d040-127">通常可以从“[应用程序事件日志](#application-event-log)”和“[ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)”的条目中确定进程启动失败的原因。</span><span class="sxs-lookup"><span data-stu-id="2d040-127">The cause of a process startup failure is usually determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="2d040-128">500.31 ANCM 找不到本机依赖项</span><span class="sxs-lookup"><span data-stu-id="2d040-128">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="2d040-129">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="2d040-129">The worker process fails.</span></span> <span data-ttu-id="2d040-130">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="2d040-130">The app doesn't start.</span></span>

<span data-ttu-id="2d040-131">ASP.NET Core 模块尝试进程内启动 .NET Core 运行时，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="2d040-131">The ASP.NET Core Module attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="2d040-132">此类启动失败的最常见原因是未安装 `Microsoft.NETCore.App` 或 `Microsoft.AspNetCore.App`运行时。</span><span class="sxs-lookup"><span data-stu-id="2d040-132">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="2d040-133">如果将应用部署为面向 ASP.NET Core 3.0，并且计算机上不存在该版本，则会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="2d040-133">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="2d040-134">示例错误消息如下所示：</span><span class="sxs-lookup"><span data-stu-id="2d040-134">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="2d040-135">错误消息列出了所有已安装的 .NET Core 版本以及应用请求的版本。</span><span class="sxs-lookup"><span data-stu-id="2d040-135">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="2d040-136">请通过以下一种方法修复此错误：</span><span class="sxs-lookup"><span data-stu-id="2d040-136">To fix this error, either:</span></span>

* <span data-ttu-id="2d040-137">在计算机上安装适当版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="2d040-137">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="2d040-138">更改应用，使其面向计算机上已存在的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="2d040-138">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="2d040-139">将应用作为[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)进行发布。</span><span class="sxs-lookup"><span data-stu-id="2d040-139">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="2d040-140">在开发过程（`ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`）中运行时，HTTP 响应中会写入特定的错误。</span><span class="sxs-lookup"><span data-stu-id="2d040-140">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="2d040-141">还可以在[应用程序事件日志](#application-event-log)中找到进程启动失败的原因。</span><span class="sxs-lookup"><span data-stu-id="2d040-141">The cause of a process startup failure is also found in the [Application Event Log](#application-event-log).</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="2d040-142">500.32 ANCM 无法加载 dll</span><span class="sxs-lookup"><span data-stu-id="2d040-142">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="2d040-143">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="2d040-143">The worker process fails.</span></span> <span data-ttu-id="2d040-144">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="2d040-144">The app doesn't start.</span></span>

<span data-ttu-id="2d040-145">此错误的最常见原因是针对不兼容的处理器体系结构发布了应用。</span><span class="sxs-lookup"><span data-stu-id="2d040-145">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="2d040-146">如果工作进程作为 32 位应用运行，而将应用发布为面向 64 位，则会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="2d040-146">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="2d040-147">请通过以下一种方法修复此错误：</span><span class="sxs-lookup"><span data-stu-id="2d040-147">To fix this error, either:</span></span>

* <span data-ttu-id="2d040-148">针对同一处理器体系结构将应用作为工作进程进行重新发布。</span><span class="sxs-lookup"><span data-stu-id="2d040-148">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="2d040-149">将应用作为[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-executables-fde)进行发布。</span><span class="sxs-lookup"><span data-stu-id="2d040-149">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="2d040-150">500.33 ANCM 请求处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="2d040-150">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="2d040-151">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="2d040-151">The worker process fails.</span></span> <span data-ttu-id="2d040-152">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="2d040-152">The app doesn't start.</span></span>

<span data-ttu-id="2d040-153">应用未引用 `Microsoft.AspNetCore.App` 框架。</span><span class="sxs-lookup"><span data-stu-id="2d040-153">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="2d040-154">ASP.NET Core 模块只能托管面向 `Microsoft.AspNetCore.App` 框架的应用。</span><span class="sxs-lookup"><span data-stu-id="2d040-154">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the ASP.NET Core Module.</span></span>

<span data-ttu-id="2d040-155">要修复此错误，请确保应用面向 `Microsoft.AspNetCore.App` 框架。</span><span class="sxs-lookup"><span data-stu-id="2d040-155">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="2d040-156">检查 `.runtimeconfig.json` 以验证该应用所面向的框架。</span><span class="sxs-lookup"><span data-stu-id="2d040-156">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="2d040-157">500.34 ANCM 混合托管模型不受支持</span><span class="sxs-lookup"><span data-stu-id="2d040-157">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="2d040-158">工作进程不能在同一进程中同时运行进程内应用和进程外应用。</span><span class="sxs-lookup"><span data-stu-id="2d040-158">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="2d040-159">要修复此错误，请在单独的 IIS 应用程序池中运行应用。</span><span class="sxs-lookup"><span data-stu-id="2d040-159">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="2d040-160">500.35 ANCM 同一进程内有多个进程内应用程序</span><span class="sxs-lookup"><span data-stu-id="2d040-160">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="2d040-161">工作进程不能在同一进程中同时运行进程内应用和进程外应用。</span><span class="sxs-lookup"><span data-stu-id="2d040-161">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="2d040-162">要修复此错误，请在单独的 IIS 应用程序池中运行应用。</span><span class="sxs-lookup"><span data-stu-id="2d040-162">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="2d040-163">500.36 ANCM 进程外处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="2d040-163">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="2d040-164">进程外请求处理程序 aspnetcorev2_outofprocess.dll 未与 aspnetcorev2.dll 文件相邻   。</span><span class="sxs-lookup"><span data-stu-id="2d040-164">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="2d040-165">这表示 ASP.NET Core 模块的安装已损坏。</span><span class="sxs-lookup"><span data-stu-id="2d040-165">This indicates a corrupted installation of the ASP.NET Core Module.</span></span>

<span data-ttu-id="2d040-166">要修复此错误，请修复 [.NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)（对于 IIS）或 Visual Studio（对于 IIS Express）的安装。</span><span class="sxs-lookup"><span data-stu-id="2d040-166">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="2d040-167">500.37 ANCM 无法在启动时间限制内启动</span><span class="sxs-lookup"><span data-stu-id="2d040-167">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="2d040-168">ANCM 无法在提供的启动时间限制内启动。</span><span class="sxs-lookup"><span data-stu-id="2d040-168">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="2d040-169">默认情况下，超时时间为 120 秒。</span><span class="sxs-lookup"><span data-stu-id="2d040-169">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="2d040-170">在同一台计算机上启动大量应用时，则可能发生此错误。</span><span class="sxs-lookup"><span data-stu-id="2d040-170">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="2d040-171">在启动期间检查服务器上的 CPU/内存使用峰值。</span><span class="sxs-lookup"><span data-stu-id="2d040-171">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="2d040-172">可能需要交错执行多个应用程序的启动进程。</span><span class="sxs-lookup"><span data-stu-id="2d040-172">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="2d040-173">500.30 进程内启动失败</span><span class="sxs-lookup"><span data-stu-id="2d040-173">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="2d040-174">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="2d040-174">The worker process fails.</span></span> <span data-ttu-id="2d040-175">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="2d040-175">The app doesn't start.</span></span>

<span data-ttu-id="2d040-176">ASP.NET Core 模块尝试进程内启动 .NET Core 运行时，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="2d040-176">The ASP.NET Core Module attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="2d040-177">通常可以从“[应用程序事件日志](#application-event-log)”和“[ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)”的条目中确定进程启动失败的原因。</span><span class="sxs-lookup"><span data-stu-id="2d040-177">The cause of a process startup failure is usually determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="2d040-178">500.0 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="2d040-178">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="2d040-179">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="2d040-179">The worker process fails.</span></span> <span data-ttu-id="2d040-180">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="2d040-180">The app doesn't start.</span></span>

<span data-ttu-id="2d040-181">还可以在[应用程序事件日志](#application-event-log)中找到进程启动失败的原因。</span><span class="sxs-lookup"><span data-stu-id="2d040-181">The cause of a process startup failure is also found in the [Application Event Log](#application-event-log).</span></span>

::: moniker-end

<span data-ttu-id="2d040-182">**连接重置**</span><span class="sxs-lookup"><span data-stu-id="2d040-182">**Connection reset**</span></span>

<span data-ttu-id="2d040-183">如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”  已经太晚了。</span><span class="sxs-lookup"><span data-stu-id="2d040-183">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="2d040-184">通常在序列化响应的复杂对象期间出现错误时发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="2d040-184">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="2d040-185">此类型的错误在客户端上显示为“连接重置”  错误。</span><span class="sxs-lookup"><span data-stu-id="2d040-185">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="2d040-186">[应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。</span><span class="sxs-lookup"><span data-stu-id="2d040-186">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

## <a name="default-startup-limits"></a><span data-ttu-id="2d040-187">默认启动限制</span><span class="sxs-lookup"><span data-stu-id="2d040-187">Default startup limits</span></span>

<span data-ttu-id="2d040-188">ASP.NET Core 模块的默认“startupTimeLimit”  配置为 120 秒。</span><span class="sxs-lookup"><span data-stu-id="2d040-188">The ASP.NET Core Module is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="2d040-189">保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。</span><span class="sxs-lookup"><span data-stu-id="2d040-189">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="2d040-190">有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="2d040-190">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="2d040-191">解决应用启动错误</span><span class="sxs-lookup"><span data-stu-id="2d040-191">Troubleshoot app startup errors</span></span>

### <a name="application-event-log"></a><span data-ttu-id="2d040-192">应用程序事件日志</span><span class="sxs-lookup"><span data-stu-id="2d040-192">Application Event Log</span></span>

<span data-ttu-id="2d040-193">若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡  ：</span><span class="sxs-lookup"><span data-stu-id="2d040-193">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="2d040-194">在 Azure 门户中打开“应用服务”中的应用  。</span><span class="sxs-lookup"><span data-stu-id="2d040-194">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="2d040-195">选择“诊断并解决问题”  。</span><span class="sxs-lookup"><span data-stu-id="2d040-195">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="2d040-196">选择“诊断工具”标题  。</span><span class="sxs-lookup"><span data-stu-id="2d040-196">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="2d040-197">在“支持工具”下，选择“应用程序事件”按钮   。</span><span class="sxs-lookup"><span data-stu-id="2d040-197">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="2d040-198">检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2 条目提供的最新错误    。</span><span class="sxs-lookup"><span data-stu-id="2d040-198">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="2d040-199">使用“诊断并解决问题”  边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：</span><span class="sxs-lookup"><span data-stu-id="2d040-199">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="2d040-200">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="2d040-200">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="2d040-201">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="2d040-201">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2d040-202">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="2d040-202">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2d040-203">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="2d040-203">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="2d040-204">打开 LogFiles  文件夹。</span><span class="sxs-lookup"><span data-stu-id="2d040-204">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="2d040-205">选择 eventlog.xml  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="2d040-205">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="2d040-206">检查日志。</span><span class="sxs-lookup"><span data-stu-id="2d040-206">Examine the log.</span></span> <span data-ttu-id="2d040-207">滚动到日志底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="2d040-207">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="2d040-208">在 Kudu 控制台中运行应用</span><span class="sxs-lookup"><span data-stu-id="2d040-208">Run the app in the Kudu console</span></span>

<span data-ttu-id="2d040-209">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="2d040-209">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="2d040-210">可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：</span><span class="sxs-lookup"><span data-stu-id="2d040-210">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="2d040-211">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="2d040-211">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="2d040-212">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="2d040-212">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2d040-213">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="2d040-213">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2d040-214">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="2d040-214">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="2d040-215">测试 32 位 (x86) 应用</span><span class="sxs-lookup"><span data-stu-id="2d040-215">Test a 32-bit (x86) app</span></span>

##### <a name="current-release"></a><span data-ttu-id="2d040-216">当前版本</span><span class="sxs-lookup"><span data-stu-id="2d040-216">Current release</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="2d040-217">运行应用：</span><span class="sxs-lookup"><span data-stu-id="2d040-217">Run the app:</span></span>
   * <span data-ttu-id="2d040-218">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="2d040-218">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```console
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="2d040-219">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="2d040-219">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="2d040-220">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="2d040-220">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a><span data-ttu-id="2d040-221">在预览版上运行的依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="2d040-221">Framework-dependent deployment running on a preview release</span></span>

<span data-ttu-id="2d040-222">必须安装 ASP.NET Core {VERSION} (x86) 运行时站点扩展。 </span><span class="sxs-lookup"><span data-stu-id="2d040-222">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="2d040-223">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="2d040-223">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="2d040-224">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="2d040-224">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="2d040-225">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="2d040-225">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="2d040-226">测试 64 位 (x64) 应用</span><span class="sxs-lookup"><span data-stu-id="2d040-226">Test a 64-bit (x64) app</span></span>

##### <a name="current-release"></a><span data-ttu-id="2d040-227">当前版本</span><span class="sxs-lookup"><span data-stu-id="2d040-227">Current release</span></span>

* <span data-ttu-id="2d040-228">如果应用是 64 位 (x64) [依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="2d040-228">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="2d040-229">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="2d040-229">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="2d040-230">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="2d040-230">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="2d040-231">运行应用：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="2d040-231">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="2d040-232">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="2d040-232">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a><span data-ttu-id="2d040-233">在预览版上运行的依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="2d040-233">Framework-dependent deployment running on a preview release</span></span>

<span data-ttu-id="2d040-234">必须安装 ASP.NET Core {VERSION} (x64) 运行时站点扩展。 </span><span class="sxs-lookup"><span data-stu-id="2d040-234">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="2d040-235">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="2d040-235">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="2d040-236">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="2d040-236">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="2d040-237">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="2d040-237">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="2d040-238">ASP.NET Core 模块 stdout 日志</span><span class="sxs-lookup"><span data-stu-id="2d040-238">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="2d040-239">ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。</span><span class="sxs-lookup"><span data-stu-id="2d040-239">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="2d040-240">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2d040-240">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="2d040-241">在 Azure 门户中导航到“诊断并解决问题”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="2d040-241">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="2d040-242">在“选择问题类别”  下，选择“Web 应用关闭”  按钮。</span><span class="sxs-lookup"><span data-stu-id="2d040-242">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="2d040-243">在“建议的解决方案”   >   “启用 Stdout 日志重定向”下，选择“打开 Kudu 控制台以编辑 Web.Config”  对应的按钮。</span><span class="sxs-lookup"><span data-stu-id="2d040-243">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="2d040-244">在 Kudu 诊断控制台  中，打开路径“站点   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="2d040-244">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="2d040-245">向下滚动以在列表底部显示“web.config”  文件。</span><span class="sxs-lookup"><span data-stu-id="2d040-245">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="2d040-246">单击“web.config”  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="2d040-246">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="2d040-247">将“stdoutLogEnabled”  设置为 `true`，并将“stdoutLogFile”  路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="2d040-247">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="2d040-248">选择“保存”  以保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="2d040-248">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="2d040-249">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="2d040-249">Make a request to the app.</span></span>
1. <span data-ttu-id="2d040-250">返回到 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="2d040-250">Return to the Azure portal.</span></span> <span data-ttu-id="2d040-251">选择“开发工具”  区域中的“高级工具”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="2d040-251">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="2d040-252">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="2d040-252">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2d040-253">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="2d040-253">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2d040-254">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="2d040-254">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="2d040-255">选择“LogFiles”  文件夹。</span><span class="sxs-lookup"><span data-stu-id="2d040-255">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="2d040-256">检查“已修改”  列并选择铅笔图标以编辑具有最新修改日期的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="2d040-256">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="2d040-257">打开日志文件后，将显示错误。</span><span class="sxs-lookup"><span data-stu-id="2d040-257">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="2d040-258">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="2d040-258">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="2d040-259">在 Kudu 诊断控制台  中，返回到路径“site   > wwwroot  ”以显示 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="2d040-259">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="2d040-260">通过选择铅笔图标再次打开 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="2d040-260">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="2d040-261">将“stdoutLogEnabled”  设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="2d040-261">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="2d040-262">选择“保存”  以保存文件。</span><span class="sxs-lookup"><span data-stu-id="2d040-262">Select **Save** to save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="2d040-263">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="2d040-263">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="2d040-264">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="2d040-264">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="2d040-265">仅使用 stdout 日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="2d040-265">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="2d040-266">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="2d040-266">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2d040-267">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="2d040-267">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log"></a><span data-ttu-id="2d040-268">ASP.NET Core 模块调试日志</span><span class="sxs-lookup"><span data-stu-id="2d040-268">ASP.NET Core Module debug log</span></span>

<span data-ttu-id="2d040-269">ASP.NET Core 模块调试日志从 ASP.NET Core 模块提供了更多、更详细的日志记录。</span><span class="sxs-lookup"><span data-stu-id="2d040-269">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="2d040-270">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2d040-270">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="2d040-271">要启用增强的诊断日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="2d040-271">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="2d040-272">按照[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中的说明配置应用以获取增强的诊断日志记录。</span><span class="sxs-lookup"><span data-stu-id="2d040-272">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="2d040-273">重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="2d040-273">Redeploy the app.</span></span>
   * <span data-ttu-id="2d040-274">使用 Kudu 控制台将[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中显示的 `<handlerSettings>` 添加到动态应用的 web.config 文件中  ：</span><span class="sxs-lookup"><span data-stu-id="2d040-274">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="2d040-275">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="2d040-275">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="2d040-276">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="2d040-276">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2d040-277">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="2d040-277">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="2d040-278">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="2d040-278">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="2d040-279">打开路径“site   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="2d040-279">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="2d040-280">通过选择铅笔按钮编辑 web.config 文件  。</span><span class="sxs-lookup"><span data-stu-id="2d040-280">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="2d040-281">添加 `<handlerSettings>` 部分（如[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所示）。</span><span class="sxs-lookup"><span data-stu-id="2d040-281">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="2d040-282">选择“保存”按钮  。</span><span class="sxs-lookup"><span data-stu-id="2d040-282">Select the **Save** button.</span></span>
1. <span data-ttu-id="2d040-283">打开“开发工具”区域中的“高级工具”   。</span><span class="sxs-lookup"><span data-stu-id="2d040-283">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="2d040-284">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="2d040-284">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2d040-285">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="2d040-285">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2d040-286">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="2d040-286">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="2d040-287">打开路径“site   > wwwroot  ”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="2d040-287">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="2d040-288">如果没有为 aspnetcore-debug.log 文件提供路径，则该文件将显示在列表中  。</span><span class="sxs-lookup"><span data-stu-id="2d040-288">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="2d040-289">如果提供了路径，请导航到日志文件的位置。</span><span class="sxs-lookup"><span data-stu-id="2d040-289">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="2d040-290">使用文件名旁边的铅笔按钮打开日志文件。</span><span class="sxs-lookup"><span data-stu-id="2d040-290">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="2d040-291">故障排除完成后，禁用调试日志记录：</span><span class="sxs-lookup"><span data-stu-id="2d040-291">Disable debug logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="2d040-292">要禁用增强的调试日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="2d040-292">To disable the enhanced debug log, perform either of the following:</span></span>
   * <span data-ttu-id="2d040-293">从本地删除 web.config 文件中的 `<handlerSettings>` 并重新部署该应用  。</span><span class="sxs-lookup"><span data-stu-id="2d040-293">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
   * <span data-ttu-id="2d040-294">使用 Kudu 控制台编辑 web.config 文件并删除 `<handlerSettings>` 部分  。</span><span class="sxs-lookup"><span data-stu-id="2d040-294">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="2d040-295">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="2d040-295">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="2d040-296">无法禁用调试日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="2d040-296">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="2d040-297">日志文件大小没有任何限制。</span><span class="sxs-lookup"><span data-stu-id="2d040-297">There's no limit on log file size.</span></span> <span data-ttu-id="2d040-298">仅使用调试日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="2d040-298">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="2d040-299">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="2d040-299">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2d040-300">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="2d040-300">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker-end

## <a name="common-startup-errors"></a><span data-ttu-id="2d040-301">常见启动错误</span><span class="sxs-lookup"><span data-stu-id="2d040-301">Common startup errors</span></span>

<span data-ttu-id="2d040-302">请参阅 <xref:host-and-deploy/azure-iis-errors-reference>。</span><span class="sxs-lookup"><span data-stu-id="2d040-302">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="2d040-303">参考主题介绍了阻止应用启动的大部分常见问题。</span><span class="sxs-lookup"><span data-stu-id="2d040-303">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="slow-or-hanging-app"></a><span data-ttu-id="2d040-304">应用缓慢或挂起</span><span class="sxs-lookup"><span data-stu-id="2d040-304">Slow or hanging app</span></span>

<span data-ttu-id="2d040-305">如果应用程序响应缓慢或在有请求时挂起，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="2d040-305">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="2d040-306">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="2d040-306">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="2d040-307">使用故障诊断程序站点扩展来捕获 Azure Web 应用程序上间歇性异常问题或性能问题的转储</span><span class="sxs-lookup"><span data-stu-id="2d040-307">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

## <a name="remote-debugging"></a><span data-ttu-id="2d040-308">远程调试</span><span class="sxs-lookup"><span data-stu-id="2d040-308">Remote debugging</span></span>

<span data-ttu-id="2d040-309">请参见下面的主题：</span><span class="sxs-lookup"><span data-stu-id="2d040-309">See the following topics:</span></span>

* <span data-ttu-id="2d040-310">[使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除的远程调试 Web 应用部分](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="2d040-310">[Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) (Azure documentation)</span></span>
* <span data-ttu-id="2d040-311">[在 Visual Studio 2017 的 Azure 中的 IIS 上远程调试 ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)（Visual Studio 文档）</span><span class="sxs-lookup"><span data-stu-id="2d040-311">[Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure) (Visual Studio documentation)</span></span>

## <a name="application-insights"></a><span data-ttu-id="2d040-312">Application Insights</span><span class="sxs-lookup"><span data-stu-id="2d040-312">Application Insights</span></span>

<span data-ttu-id="2d040-313">[Application Insights](https://azure.microsoft.com/services/application-insights/) 提供来自 Azure 应用服务中托管的应用的遥测，包括错误日志记录和报告功能。</span><span class="sxs-lookup"><span data-stu-id="2d040-313">[Application Insights](https://azure.microsoft.com/services/application-insights/) provides telemetry from apps hosted in the Azure App Service, including error logging and reporting features.</span></span> <span data-ttu-id="2d040-314">当应用的日志记录功能变得可用时，Application Insights 只能报告应用启动后出现的错误。</span><span class="sxs-lookup"><span data-stu-id="2d040-314">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="2d040-315">有关详细信息，请参阅[用于 ASP.NET Core 的 Application Insights](/azure/application-insights/app-insights-asp-net-core)。</span><span class="sxs-lookup"><span data-stu-id="2d040-315">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="monitoring-blades"></a><span data-ttu-id="2d040-316">监视边栏选项卡</span><span class="sxs-lookup"><span data-stu-id="2d040-316">Monitoring blades</span></span>

<span data-ttu-id="2d040-317">监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。</span><span class="sxs-lookup"><span data-stu-id="2d040-317">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="2d040-318">这些边栏选项卡可用于诊断 500 系列错误。</span><span class="sxs-lookup"><span data-stu-id="2d040-318">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="2d040-319">确认是否已安装 ASP.NET Core 扩展。</span><span class="sxs-lookup"><span data-stu-id="2d040-319">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="2d040-320">如果未安装扩展，请手动进行安装：</span><span class="sxs-lookup"><span data-stu-id="2d040-320">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="2d040-321">在“开发工具”  边栏选项卡部分中，选择“扩展”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="2d040-321">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="2d040-322">“ASP.NET Core 扩展”  应显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="2d040-322">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="2d040-323">如果未安装扩展，请选择“添加”  按钮。</span><span class="sxs-lookup"><span data-stu-id="2d040-323">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="2d040-324">从列表中选择“ASP.NET Core 扩展”  。</span><span class="sxs-lookup"><span data-stu-id="2d040-324">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="2d040-325">选择“确定”  以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="2d040-325">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="2d040-326">选择“添加扩展”  边栏选项卡上的“确定”  。</span><span class="sxs-lookup"><span data-stu-id="2d040-326">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="2d040-327">信息性弹出消息指示成功安装扩展的时间。</span><span class="sxs-lookup"><span data-stu-id="2d040-327">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="2d040-328">如果未启用 stdout 日志记录，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2d040-328">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="2d040-329">在 Azure 门户中，选择“开发工具”  区域中的“高级工具”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="2d040-329">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="2d040-330">选择“转到&rarr;”  按钮。</span><span class="sxs-lookup"><span data-stu-id="2d040-330">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="2d040-331">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="2d040-331">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="2d040-332">使用页面顶部的导航栏，打开“调试控制台”  并选择“CMD”  。</span><span class="sxs-lookup"><span data-stu-id="2d040-332">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="2d040-333">打开路径“site   > wwwroot  ”下的文件夹，然后向下滚动以显示列表底部的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="2d040-333">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="2d040-334">单击“web.config”  文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="2d040-334">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="2d040-335">将“stdoutLogEnabled”  设置为 `true`，并将“stdoutLogFile”  路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="2d040-335">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="2d040-336">选择“保存”  以保存已更新的 web.config  文件。</span><span class="sxs-lookup"><span data-stu-id="2d040-336">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="2d040-337">继续激活诊断日志记录：</span><span class="sxs-lookup"><span data-stu-id="2d040-337">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="2d040-338">在 Azure 门户中，选择“诊断日志”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="2d040-338">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="2d040-339">选择“应用程序日志记录(文件系统)”  和“详细错误消息”  的“开”  开关。</span><span class="sxs-lookup"><span data-stu-id="2d040-339">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="2d040-340">选择边栏选项卡顶部的“保存”  按钮。</span><span class="sxs-lookup"><span data-stu-id="2d040-340">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="2d040-341">若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”  的“开”  开关。</span><span class="sxs-lookup"><span data-stu-id="2d040-341">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="2d040-342">选择“日志流”  边栏选项卡，将在门户中的“诊断日志”  边栏选项卡下立即列出。</span><span class="sxs-lookup"><span data-stu-id="2d040-342">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="2d040-343">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="2d040-343">Make a request to the app.</span></span>
1. <span data-ttu-id="2d040-344">在日志流数据中，指示了错误的原因。</span><span class="sxs-lookup"><span data-stu-id="2d040-344">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="2d040-345">故障排除完成后，请务必禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="2d040-345">Be sure to disable stdout logging when troubleshooting is complete.</span></span> <span data-ttu-id="2d040-346">请参阅 [ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)部分中的说明。</span><span class="sxs-lookup"><span data-stu-id="2d040-346">See the instructions in the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) section.</span></span>

<span data-ttu-id="2d040-347">若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2d040-347">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="2d040-348">在 Azure 门户中导航到“诊断并解决问题”  边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="2d040-348">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="2d040-349">从侧栏的“支持工具”  区域中选择“失败请求跟踪日志”  。</span><span class="sxs-lookup"><span data-stu-id="2d040-349">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="2d040-350">有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="2d040-350">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="2d040-351">有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="2d040-351">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="2d040-352">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="2d040-352">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="2d040-353">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="2d040-353">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="2d040-354">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="2d040-354">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2d040-355">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="2d040-355">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2d040-356">其他资源</span><span class="sxs-lookup"><span data-stu-id="2d040-356">Additional resources</span></span>

* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="2d040-357">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="2d040-357">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="2d040-358">解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误</span><span class="sxs-lookup"><span data-stu-id="2d040-358">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="2d040-359">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="2d040-359">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="2d040-360">Azure 中的 Web 应用的应用程序性能常见问题</span><span class="sxs-lookup"><span data-stu-id="2d040-360">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="2d040-361">Azure Web 应用沙盒（应用服务运行时执行限制）</span><span class="sxs-lookup"><span data-stu-id="2d040-361">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="2d040-362">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="2d040-362">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
