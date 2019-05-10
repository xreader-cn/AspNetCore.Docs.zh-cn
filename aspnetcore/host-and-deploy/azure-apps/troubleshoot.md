---
title: 对 Azure 应用服务上的 ASP.NET Core 进行故障排除
author: guardrex
description: 了解如何诊断 ASP.NET Core Azure 应用服务部署问题。
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2019
uid: host-and-deploy/azure-apps/troubleshoot
ms.openlocfilehash: 36c2bdfa585a0fd54ca93bf4c0edb4cf6f7d934a
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64886902"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service"></a><span data-ttu-id="6f5e7-103">对 Azure 应用服务上的 ASP.NET Core 进行故障排除</span><span class="sxs-lookup"><span data-stu-id="6f5e7-103">Troubleshoot ASP.NET Core on Azure App Service</span></span>

<span data-ttu-id="6f5e7-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6f5e7-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="6f5e7-105">本文说明了如何使用 Azure 应用服务的诊断工具诊断 ASP.NET Core 应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue using Azure App Service's diagnostic tools.</span></span> <span data-ttu-id="6f5e7-106">有关其他故障排除建议，请参阅 Azure 文档中的 [Azure 应用服务诊断概述](/azure/app-service/app-service-diagnostics)和[如何：在 Azure 应用服务中监视应用](/azure/app-service/web-sites-monitor)。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-106">For additional troubleshooting advice, see [Azure App Service diagnostics overview](/azure/app-service/app-service-diagnostics) and [How to: Monitor Apps in Azure App Service](/azure/app-service/web-sites-monitor) in the Azure documentation.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="6f5e7-107">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="6f5e7-107">App startup errors</span></span>

<span data-ttu-id="6f5e7-108">**502.5 进程故障**工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-108">**502.5 Process Failure** The worker process fails.</span></span> <span data-ttu-id="6f5e7-109">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-109">The app doesn't start.</span></span>

<span data-ttu-id="6f5e7-110">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试启动工作进程，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-110">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="6f5e7-111">检查应用程序事件日志通常可帮助解决此类型的问题。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-111">Examining the Application Event Log often helps troubleshoot this type of problem.</span></span> <span data-ttu-id="6f5e7-112">[应用程序事件日志](#application-event-log)部分中介绍了访问日志。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-112">Accessing the log is explained in the [Application Event Log](#application-event-log) section.</span></span>

<span data-ttu-id="6f5e7-113">配置错误的应用导致工作进程失败时，将返回“502.5 进程故障”错误页面：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-113">The *502.5 Process Failure* error page is returned when a misconfigured app causes the worker process to fail:</span></span>

![显示“502.5 进程故障”页面的浏览器窗口](troubleshoot/_static/process-failure-page.png)

<span data-ttu-id="6f5e7-115">**500 内部服务器错误**</span><span class="sxs-lookup"><span data-stu-id="6f5e7-115">**500 Internal Server Error**</span></span>

<span data-ttu-id="6f5e7-116">应用启动，但某个错误阻止了服务器完成请求。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-116">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="6f5e7-117">在启动期间或在创建响应时，应用的代码内出现此错误。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-117">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="6f5e7-118">响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-118">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="6f5e7-119">应用程序事件日志通常表明应用正常启动。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-119">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="6f5e7-120">从服务器的角度来看，这是正确的。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-120">From the server's perspective, that's correct.</span></span> <span data-ttu-id="6f5e7-121">应用已启动，但无法生成有效的响应。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-121">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="6f5e7-122">[在 Kudu 控制台中运行应用](#run-the-app-in-the-kudu-console)或[启用 ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)以解决该问题。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-122">[Run the app in the Kudu console](#run-the-app-in-the-kudu-console) or [enable the ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) to troubleshoot the problem.</span></span>

<span data-ttu-id="6f5e7-123">**连接重置**</span><span class="sxs-lookup"><span data-stu-id="6f5e7-123">**Connection reset**</span></span>

<span data-ttu-id="6f5e7-124">如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”已经太晚了。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-124">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="6f5e7-125">通常在序列化响应的复杂对象期间出现错误时发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-125">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="6f5e7-126">此类型的错误在客户端上显示为“连接重置”错误。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-126">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="6f5e7-127">[应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-127">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

## <a name="default-startup-limits"></a><span data-ttu-id="6f5e7-128">默认启动限制</span><span class="sxs-lookup"><span data-stu-id="6f5e7-128">Default startup limits</span></span>

<span data-ttu-id="6f5e7-129">ASP.NET Core 模块的默认“startupTimeLimit”配置为 120 秒。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-129">The ASP.NET Core Module is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="6f5e7-130">保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-130">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="6f5e7-131">有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-131">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="6f5e7-132">解决应用启动错误</span><span class="sxs-lookup"><span data-stu-id="6f5e7-132">Troubleshoot app startup errors</span></span>

### <a name="application-event-log"></a><span data-ttu-id="6f5e7-133">应用程序事件日志</span><span class="sxs-lookup"><span data-stu-id="6f5e7-133">Application Event Log</span></span>

<span data-ttu-id="6f5e7-134">若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-134">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="6f5e7-135">在 Azure 门户中打开“应用服务”中的应用。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-135">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="6f5e7-136">选择“诊断并解决问题”。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-136">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="6f5e7-137">选择“诊断工具”标题。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-137">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="6f5e7-138">在“支持工具”下，选择“应用程序事件”按钮。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-138">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="6f5e7-139">检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2 条目提供的最新错误。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-139">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="6f5e7-140">使用“诊断并解决问题”边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-140">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="6f5e7-141">打开“开发工具”区域中的“高级工具”。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-141">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="6f5e7-142">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-142">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="6f5e7-143">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-143">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="6f5e7-144">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-144">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="6f5e7-145">打开 LogFiles 文件夹。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-145">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="6f5e7-146">选择 eventlog.xml 文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-146">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="6f5e7-147">检查日志。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-147">Examine the log.</span></span> <span data-ttu-id="6f5e7-148">滚动到日志底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-148">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="6f5e7-149">在 Kudu 控制台中运行应用</span><span class="sxs-lookup"><span data-stu-id="6f5e7-149">Run the app in the Kudu console</span></span>

<span data-ttu-id="6f5e7-150">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-150">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="6f5e7-151">可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-151">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="6f5e7-152">打开“开发工具”区域中的“高级工具”。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-152">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="6f5e7-153">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-153">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="6f5e7-154">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-154">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="6f5e7-155">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-155">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="6f5e7-156">测试 32 位 (x86) 应用</span><span class="sxs-lookup"><span data-stu-id="6f5e7-156">Test a 32-bit (x86) app</span></span>

##### <a name="current-release"></a><span data-ttu-id="6f5e7-157">当前版本</span><span class="sxs-lookup"><span data-stu-id="6f5e7-157">Current release</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="6f5e7-158">运行应用：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-158">Run the app:</span></span>
   * <span data-ttu-id="6f5e7-159">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-159">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```console
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="6f5e7-160">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-160">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="6f5e7-161">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-161">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a><span data-ttu-id="6f5e7-162">在预览版上运行的依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="6f5e7-162">Framework-dependent deployment running on a preview release</span></span>

<span data-ttu-id="6f5e7-163">必须安装 ASP.NET Core {VERSION} (x86) 运行时站点扩展。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-163">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="6f5e7-164">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="6f5e7-164">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="6f5e7-165">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="6f5e7-165">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="6f5e7-166">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-166">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="6f5e7-167">测试 64 位 (x64) 应用</span><span class="sxs-lookup"><span data-stu-id="6f5e7-167">Test a 64-bit (x64) app</span></span>

##### <a name="current-release"></a><span data-ttu-id="6f5e7-168">当前版本</span><span class="sxs-lookup"><span data-stu-id="6f5e7-168">Current release</span></span>

* <span data-ttu-id="6f5e7-169">如果应用是 64 位 (x64) [依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-169">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="6f5e7-170">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="6f5e7-170">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="6f5e7-171">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-171">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="6f5e7-172">运行应用：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="6f5e7-172">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="6f5e7-173">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-173">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a><span data-ttu-id="6f5e7-174">在预览版上运行的依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="6f5e7-174">Framework-dependent deployment running on a preview release</span></span>

<span data-ttu-id="6f5e7-175">必须安装 ASP.NET Core {VERSION} (x64) 运行时站点扩展。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-175">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="6f5e7-176">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="6f5e7-176">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="6f5e7-177">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="6f5e7-177">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="6f5e7-178">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-178">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="6f5e7-179">ASP.NET Core 模块 stdout 日志</span><span class="sxs-lookup"><span data-stu-id="6f5e7-179">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="6f5e7-180">ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-180">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="6f5e7-181">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-181">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="6f5e7-182">在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-182">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="6f5e7-183">在“选择问题类别”下，选择“Web 应用关闭”按钮。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-183">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="6f5e7-184">在“建议的解决方案” > “启用 Stdout 日志重定向”下，选择“打开 Kudu 控制台以编辑 Web.Config”对应的按钮。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-184">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="6f5e7-185">在 Kudu 诊断控制台中，打开路径“站点 > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-185">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="6f5e7-186">向下滚动以在列表底部显示“web.config”文件。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-186">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="6f5e7-187">单击“web.config”文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-187">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="6f5e7-188">将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-188">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="6f5e7-189">选择“保存”以保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-189">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="6f5e7-190">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-190">Make a request to the app.</span></span>
1. <span data-ttu-id="6f5e7-191">返回到 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-191">Return to the Azure portal.</span></span> <span data-ttu-id="6f5e7-192">选择“开发工具”区域中的“高级工具”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-192">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="6f5e7-193">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-193">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="6f5e7-194">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-194">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="6f5e7-195">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-195">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="6f5e7-196">选择“LogFiles”文件夹。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-196">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="6f5e7-197">检查“已修改”列并选择铅笔图标以编辑具有最新修改日期的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-197">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="6f5e7-198">打开日志文件后，将显示错误。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-198">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="6f5e7-199">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-199">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="6f5e7-200">在 Kudu 诊断控制台中，返回到路径“site > wwwroot”以显示 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-200">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="6f5e7-201">通过选择铅笔图标再次打开 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-201">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="6f5e7-202">将“stdoutLogEnabled”设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-202">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="6f5e7-203">选择“保存”以保存文件。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-203">Select **Save** to save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="6f5e7-204">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-204">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="6f5e7-205">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-205">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="6f5e7-206">仅使用 stdout 日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-206">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="6f5e7-207">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-207">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="6f5e7-208">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-208">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log"></a><span data-ttu-id="6f5e7-209">ASP.NET Core 模块调试日志</span><span class="sxs-lookup"><span data-stu-id="6f5e7-209">ASP.NET Core Module debug log</span></span>

<span data-ttu-id="6f5e7-210">ASP.NET Core 模块调试日志从 ASP.NET Core 模块提供了更多、更详细的日志记录。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-210">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="6f5e7-211">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-211">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="6f5e7-212">要启用增强的诊断日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-212">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="6f5e7-213">按照[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中的说明配置应用以获取增强的诊断日志记录。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-213">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="6f5e7-214">重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-214">Redeploy the app.</span></span>
   * <span data-ttu-id="6f5e7-215">使用 Kudu 控制台将[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中显示的 `<handlerSettings>` 添加到动态应用的 web.config 文件中：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-215">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="6f5e7-216">打开“开发工具”区域中的“高级工具”。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-216">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="6f5e7-217">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-217">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="6f5e7-218">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-218">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="6f5e7-219">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-219">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="6f5e7-220">打开路径“site > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-220">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="6f5e7-221">通过选择铅笔按钮编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-221">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="6f5e7-222">添加 `<handlerSettings>` 部分（如[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所示）。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-222">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="6f5e7-223">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-223">Select the **Save** button.</span></span>
1. <span data-ttu-id="6f5e7-224">打开“开发工具”区域中的“高级工具”。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-224">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="6f5e7-225">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-225">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="6f5e7-226">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-226">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="6f5e7-227">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-227">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="6f5e7-228">打开路径“site > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-228">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="6f5e7-229">如果没有为 aspnetcore-debug.log 文件提供路径，则该文件将显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-229">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="6f5e7-230">如果提供了路径，请导航到日志文件的位置。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-230">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="6f5e7-231">使用文件名旁边的铅笔按钮打开日志文件。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-231">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="6f5e7-232">故障排除完成后，禁用调试日志记录：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-232">Disable debug logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="6f5e7-233">要禁用增强的调试日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-233">To disable the enhanced debug log, perform either of the following:</span></span>
   * <span data-ttu-id="6f5e7-234">从本地删除 web.config 文件中的 `<handlerSettings>` 并重新部署该应用。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-234">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
   * <span data-ttu-id="6f5e7-235">使用 Kudu 控制台编辑 web.config 文件并删除 `<handlerSettings>` 部分。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-235">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="6f5e7-236">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-236">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="6f5e7-237">无法禁用调试日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-237">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="6f5e7-238">日志文件大小没有任何限制。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-238">There's no limit on log file size.</span></span> <span data-ttu-id="6f5e7-239">仅使用调试日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-239">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="6f5e7-240">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-240">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="6f5e7-241">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-241">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

::: moniker-end

## <a name="common-startup-errors"></a><span data-ttu-id="6f5e7-242">常见启动错误</span><span class="sxs-lookup"><span data-stu-id="6f5e7-242">Common startup errors</span></span>

<span data-ttu-id="6f5e7-243">请参阅 <xref:host-and-deploy/azure-iis-errors-reference>。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-243">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="6f5e7-244">参考主题介绍了阻止应用启动的大部分常见问题。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-244">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="slow-or-hanging-app"></a><span data-ttu-id="6f5e7-245">应用缓慢或挂起</span><span class="sxs-lookup"><span data-stu-id="6f5e7-245">Slow or hanging app</span></span>

<span data-ttu-id="6f5e7-246">如果应用程序响应缓慢或在有请求时挂起，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-246">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="6f5e7-247">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="6f5e7-247">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="6f5e7-248">使用故障诊断程序站点扩展来捕获 Azure Web 应用程序上间歇性异常问题或性能问题的转储</span><span class="sxs-lookup"><span data-stu-id="6f5e7-248">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

## <a name="remote-debugging"></a><span data-ttu-id="6f5e7-249">远程调试</span><span class="sxs-lookup"><span data-stu-id="6f5e7-249">Remote debugging</span></span>

<span data-ttu-id="6f5e7-250">请参见下面的主题：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-250">See the following topics:</span></span>

* <span data-ttu-id="6f5e7-251">[使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除的远程调试 Web 应用部分](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="6f5e7-251">[Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) (Azure documentation)</span></span>
* <span data-ttu-id="6f5e7-252">[在 Visual Studio 2017 的 Azure 中的 IIS 上远程调试 ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)（Visual Studio 文档）</span><span class="sxs-lookup"><span data-stu-id="6f5e7-252">[Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure) (Visual Studio documentation)</span></span>

## <a name="application-insights"></a><span data-ttu-id="6f5e7-253">Application Insights</span><span class="sxs-lookup"><span data-stu-id="6f5e7-253">Application Insights</span></span>

<span data-ttu-id="6f5e7-254">[Application Insights](https://azure.microsoft.com/services/application-insights/) 提供来自 Azure 应用服务中托管的应用的遥测，包括错误日志记录和报告功能。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-254">[Application Insights](https://azure.microsoft.com/services/application-insights/) provides telemetry from apps hosted in the Azure App Service, including error logging and reporting features.</span></span> <span data-ttu-id="6f5e7-255">当应用的日志记录功能变得可用时，Application Insights 只能报告应用启动后出现的错误。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-255">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="6f5e7-256">有关详细信息，请参阅[用于 ASP.NET Core 的 Application Insights](/azure/application-insights/app-insights-asp-net-core)。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-256">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="monitoring-blades"></a><span data-ttu-id="6f5e7-257">监视边栏选项卡</span><span class="sxs-lookup"><span data-stu-id="6f5e7-257">Monitoring blades</span></span>

<span data-ttu-id="6f5e7-258">监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-258">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="6f5e7-259">这些边栏选项卡可用于诊断 500 系列错误。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-259">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="6f5e7-260">确认是否已安装 ASP.NET Core 扩展。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-260">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="6f5e7-261">如果未安装扩展，请手动进行安装：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-261">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="6f5e7-262">在“开发工具”边栏选项卡部分中，选择“扩展”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-262">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="6f5e7-263">“ASP.NET Core 扩展”应显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-263">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="6f5e7-264">如果未安装扩展，请选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-264">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="6f5e7-265">从列表中选择“ASP.NET Core 扩展”。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-265">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="6f5e7-266">选择“确定”以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-266">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="6f5e7-267">选择“添加扩展”边栏选项卡上的“确定”。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-267">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="6f5e7-268">信息性弹出消息指示成功安装扩展的时间。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-268">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="6f5e7-269">如果未启用 stdout 日志记录，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-269">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="6f5e7-270">在 Azure 门户中，选择“开发工具”区域中的“高级工具”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-270">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="6f5e7-271">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-271">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="6f5e7-272">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-272">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="6f5e7-273">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-273">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="6f5e7-274">打开路径“site > wwwroot”下的文件夹，然后向下滚动以显示列表底部的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-274">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="6f5e7-275">单击“web.config”文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-275">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="6f5e7-276">将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-276">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="6f5e7-277">选择“保存”以保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-277">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="6f5e7-278">继续激活诊断日志记录：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-278">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="6f5e7-279">在 Azure 门户中，选择“诊断日志”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-279">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="6f5e7-280">选择“应用程序日志记录(文件系统)”和“详细错误消息”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-280">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="6f5e7-281">选择边栏选项卡顶部的“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-281">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="6f5e7-282">若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-282">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="6f5e7-283">选择“日志流”边栏选项卡，将在门户中的“诊断日志”边栏选项卡下立即列出。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-283">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="6f5e7-284">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-284">Make a request to the app.</span></span>
1. <span data-ttu-id="6f5e7-285">在日志流数据中，指示了错误的原因。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-285">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="6f5e7-286">故障排除完成后，请务必禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-286">Be sure to disable stdout logging when troubleshooting is complete.</span></span> <span data-ttu-id="6f5e7-287">请参阅 [ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)部分中的说明。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-287">See the instructions in the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) section.</span></span>

<span data-ttu-id="6f5e7-288">若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="6f5e7-288">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="6f5e7-289">在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-289">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="6f5e7-290">从侧栏的“支持工具”区域中选择“失败请求跟踪日志”。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-290">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="6f5e7-291">有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-291">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="6f5e7-292">有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-292">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="6f5e7-293">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-293">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="6f5e7-294">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-294">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="6f5e7-295">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-295">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="6f5e7-296">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="6f5e7-296">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f5e7-297">其他资源</span><span class="sxs-lookup"><span data-stu-id="6f5e7-297">Additional resources</span></span>

* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="6f5e7-298">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="6f5e7-298">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="6f5e7-299">解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误</span><span class="sxs-lookup"><span data-stu-id="6f5e7-299">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="6f5e7-300">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="6f5e7-300">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="6f5e7-301">Azure 中的 Web 应用的应用程序性能常见问题</span><span class="sxs-lookup"><span data-stu-id="6f5e7-301">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="6f5e7-302">Azure Web 应用沙盒（应用服务运行时执行限制）</span><span class="sxs-lookup"><span data-stu-id="6f5e7-302">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="6f5e7-303">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="6f5e7-303">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
